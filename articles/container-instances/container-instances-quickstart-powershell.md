---
title: Rövid útmutató – Docker-tároló üzembe helyezése tárolópéldányon – PowerShell
description: Ebben a rövid útmutatóban a Azure PowerShell egy elkülönített Azure-tárolópéldányban futó tárolóba helyezett webalkalmazás gyors üzembe helyezéséhez
services: container-instances
manager: gwallace
ms.date: 03/21/2019
ms.topic: quickstart
ms.service: container-instances
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: f9c6bac45e2e7fe18895a16831f840c3ae5a9f9d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536161"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Rövid útmutató: Tárolópéldány üzembe helyezése az Azure-ban Azure PowerShell

A Azure Container Instances egyszerűséggel és sebességgel futtathat kiszolgáló nélküli Docker-tárolókat az Azure-ban. Üzembe helyezhet egy alkalmazást igény szerint egy tárolópéldányon, ha nincs szüksége egy teljes tárolóvezénylési platformra, például a Azure Kubernetes Service.

Ebben a rövid útmutatóban a Azure PowerShell egy elkülönített Windows-tárolót helyez üzembe, és elérhetővé teszi az alkalmazását egy teljes tartománynévvel (FQDN). Néhány másodperccel egyetlen üzembe helyezési parancs végrehajtása után tallózhat a tárolóban futó alkalmazáshoz:

![Az Azure Container Instances szolgáltatáshoz üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata között dönt, az oktatóanyaghoz szükség lesz a Azure PowerShell modulra. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen a [következő New-AzResourceGroup paranccsal:][New-AzResourceGroup]

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy rendelkezik egy erőforráscsoporttal, futtathat egy tárolót az Azure-ban. Ha tárolópéldányt Azure PowerShell, adja meg az erőforráscsoport nevét, a tárolópéldány nevét és a Docker-tároló rendszerképét a [New-AzContainerGroup parancsmag][New-AzContainerGroup] számára. Ebben a rövid útmutatóban a nyilvános rendszerképet `mcr.microsoft.com/windows/servercore/iis:nanoserver` használja. Ez a rendszerképcsomag Microsoft Internet Information Services (IIS) futtatására a Nano Serveren.

Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban egy DNS-névcímke használatával helyez üzembe egy tárolót, hogy az IIS nyilvánosan elérhető legyen.

A tárolópéldányok indításhoz hajtsa végre az alábbihoz hasonló parancsot. Állítson be egy egyedi értéket abban az `-DnsNameLabel` Azure-régióban, ahol a példányt létrehozza. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodpercen belül meg kell kapnia az Azure válaszát. A tároló `ProvisioningState` beállításának értéke kezdetben **Creating** (Létrehozás), de néhány percen belül **Succeeded** (Sikerült) állapotra kell váltania. Ellenőrizze az üzembe helyezés állapotát a [Get-AzContainerGroup][Get-AzContainerGroup] parancsmaggal:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

A tároló üzembe helyezési állapota, teljes tartományneve (FQDN) és IP-címe megjelenik a parancsmag kimenetében:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Miután a tároló `ProvisioningState` értéke **Succeeded** (Sikerült) állapotba vált, nyissa meg az `Fqdn`-t a böngészőben. Ha egy, az alábbihoz hasonló weboldal jelenik meg, gratulálunk! Sikeresen üzembe helyezett egy Docker-tárolóban futó alkalmazást az Azure-ban.

![Az Azure Container Instances használatával üzembe helyezett IIS képe a böngészőben][qs-powershell-01]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a tárolóval, távolítsa el a [Remove-AzContainerGroup][Remove-AzContainerGroup] parancsmaggal:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy tárolópéldányt egy a nyilvános Docker Hub beállításjegyzékben található rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
