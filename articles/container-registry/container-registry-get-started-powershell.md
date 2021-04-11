---
title: Rövid útmutató – beállításjegyzék létrehozása – PowerShell
description: Gyorsan megtudhatja, hogyan hozhat létre egy privát Docker-beállításjegyzéket Azure Container Registry a PowerShell használatával
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: b6928f1c45cdac93b70797daf41205b4c5db27e0
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283818"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Rövid útmutató: privát tároló-beállításjegyzék létrehozása Azure PowerShell használatával

Az Azure Container Registry egy felügyelt, privát Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a Docker-tárolók rendszerképeinek létrehozására, tárolására és kiszolgálására szolgál. Ebben a rövid útmutatóban megismerheti, hogyan hozhat létre Azure-tárolóregisztrációs adatbázist a PowerShell használatával. Ezután a Docker-parancsokkal leküldheti a tárolók rendszerképét a beállításjegyzékbe, és végül lekérdezheti és futtathatja a rendszerképet a beállításjegyzékből.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz Azure PowerShell modul szükséges. Futtassa a `Get-Module -ListAvailable Az` parancsot a telepített verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

A Dockert is telepítenie kell helyileg. A Docker [macOS][docker-mac], [Windows][docker-windows] és [Linux][docker-linux] operációs rendszerekhez biztosít csomagokat.

Mivel az Azure Cloud Shell nem tartalmazza az összes szükséges Docker-összetevőt (a `dockerd`-démont), ehhez a rövid útmutatóhoz nem használható a Cloud Shell.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount][Connect-AzAccount] paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Miután hitelesítette az Azure-t, hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][New-AzResourceGroup]. Az erőforráscsoport egy olyan logikai tároló, amelyben üzembe helyezheti és kezelheti az Azure-erőforrásokat.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ezután hozzon létre egy tároló-beállításjegyzéket az új erőforráscsoporthoz a [New-AzContainerRegistry][New-AzContainerRegistry] paranccsal.

A tárolóregisztrációs adatbázis nevének egyedinek kell lennie az Azure-ban, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában egy „myContainerRegistry007” nevű adatbázis jön létre. Írja be a *myContainerRegistry007* kifejezést az alábbi parancsba, majd futtassa azt az adatbázis létrehozásához:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

Ebben a rövid útmutatóban egy *alapszintű* beállításjegyzéket hozunk létre, amely egy költséghatékony megoldás a fejlesztők számára a Azure Container Registry megismeréséhez. A rendelkezésre álló szolgáltatási szintek részletes ismertetését lásd: a [Container Registry szolgáltatási szintjei][container-registry-skus].

## <a name="log-in-to-registry"></a>Bejelentkezés a beállításjegyzékbe

A tárolórendszerképek mozgatásához először be kell jelentkeznie a beállításjegyzékbe. A rövid útmutató megtartásához engedélyezze a rendszergazdai felhasználót a beállításjegyzékben a [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] paranccsal. Éles környezetekben érdemes alternatív [hitelesítési módszert](container-registry-authentication.md) használni a beállításjegyzék-hozzáféréshez, például egy egyszerű szolgáltatásnevet. 

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Ezután a bejelentkezéshez futtassa a [docker login][docker-login] parancsot:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

> [!TIP]
> Az Azure CLI biztosítja a `az acr login` parancsot, amely kényelmes módszert biztosít a tároló-beállításjegyzékbe való bejelentkezésre az [Egyéni identitásával](container-registry-authentication.md#individual-login-with-azure-ad)anélkül, hogy a Docker hitelesítő adatait kellene átadnia.


[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az ebben a rövid útmutatóban létrehozott erőforrásokkal, a [Remove-AzResourceGroup][Remove-AzResourceGroup] paranccsal távolítsa el az erőforráscsoportot, a tároló-beállításjegyzéket és az ott tárolt tároló-lemezképeket:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registryt Azure PowerShell, leküldte a tároló képét, és lehúzta és futtatta a rendszerképet a beállításjegyzékből. Folytassa a Azure Container Registry oktatóanyagokkal, és tekintse meg az ACR mélyebb megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry feladatok – oktatóanyagok][container-registry-tutorial-quick-task]

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
