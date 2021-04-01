---
title: Gyors útmutató – Docker-tároló üzembe helyezése tároló példányon – PowerShell
description: Ebben a rövid útmutatóban a Azure PowerShell használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányon futó tároló-webalkalmazást
services: container-instances
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c7002d8a83e58a9089ee3c3840b0397d63e2f198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89565582"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban Azure PowerShell használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. Igény szerint üzembe helyezhet egy alkalmazást egy tároló-példányon, ha nincs szüksége a teljes Container-előkészítési platformra, például az Azure Kubernetes szolgáltatásra.

Ebben a rövid útmutatóban a Azure PowerShell használatával telepít egy elkülönített Windows-tárolót, és az alkalmazását teljes tartománynévvel (FQDN) teszi elérhetővé. Néhány másodperccel az egyetlen központi telepítési parancs végrehajtása után megkeresheti a tárolóban futó alkalmazást:

![Az Azure Container Instances szolgáltatáshoz üzembe helyezett alkalmazás képe a böngészőben][qs-powershell-01]

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modulra van szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Container Instancest – mint minden Azure-erőforrást – egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen a következő [New-AzResourceGroup][New-AzResourceGroup] paranccsal:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Tároló létrehozása

Most, hogy rendelkezik egy erőforráscsoporttal, futtathat egy tárolót az Azure-ban. Azure PowerShell nevű tároló-példány létrehozásához adjon meg egy erőforráscsoport-nevet, egy tároló-példány nevét és a Docker-tároló rendszerképét a [New-AzContainerGroup][New-AzContainerGroup] parancsmaghoz. Ebben a rövid útmutatóban a nyilvános `mcr.microsoft.com/windows/servercore/iis:nanoserver` rendszerképet használhatja. Ez a rendszerkép a Microsoft Internet Information Services (IIS) csomagot a nano Serveren való futtatásra.

Közzéteheti a tárolókat az interneten egy vagy több port megnyitásával, egy DNS-névcímke megadásával, vagy mindkettővel. Ebben a rövid útmutatóban egy DNS-név címkével rendelkező tárolót helyez üzembe, hogy az IIS nyilvánosan elérhető legyen.

Egy tároló-példány elindításához a következőhöz hasonló parancsot kell végrehajtania. Olyan értéket állítson be `-DnsNameLabel` , amely egyedi azon az Azure-régión belül, ahol létrehozza a példányt. Ha „DNS-névcímke nem érhető el” hibaüzenetet kap, próbálkozzon másik DNS-névcímkével.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Néhány másodpercen belül meg kell kapnia az Azure válaszát. A tároló `ProvisioningState` beállításának értéke kezdetben **Creating** (Létrehozás), de néhány percen belül **Succeeded** (Sikerült) állapotra kell váltania. Tekintse meg a telepítési állapotot a [Get-AzContainerGroup][Get-AzContainerGroup] parancsmaggal:

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

Ha elkészült a tárolóval, távolítsa el a [Remove-AzContainerGroup][Remove-AzContainerGroup] parancsmaggal:

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
