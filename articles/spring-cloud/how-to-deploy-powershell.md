---
title: Az Azure Spring Cloud üzembe helyezése Azure PowerShell használatával
description: Az Azure Spring Cloud üzembe helyezése Azure PowerShell használatával
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878010"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure Spring Cloud üzembe helyezése az Azure PowerShell-lel

Ez a cikk azt ismerteti, hogyan hozható létre az Azure Spring Cloud egy példánya az az [. SpringCloud](/powershell/module/Az.SpringCloud) PowerShell-modul használatával.

## <a name="requirements"></a>Követelmények

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Míg az az **. SpringCloud** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután ez a PowerShell-modul általánosan elérhetővé válik, a jövőbeli Az PowerShell modulkiadások részévé válik, és natívan elérhető lesz az Azure Cloud Shellből.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Az Azure Spring Cloud új példányának kiépítése

Az Azure Spring Cloud új példányának létrehozásához használja a [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) parancsmagot. Az alábbi példa egy Azure Spring Cloud szolgáltatást hoz létre a korábban létrehozott erőforráscsoport megadott nevével.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Új Azure Spring Cloud-alkalmazás létrehozása

Új alkalmazás létrehozásához használja a [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) parancsmagot. Az alábbi példa egy nevű Azure Spring Cloud-alkalmazást hoz létre `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Új Azure Spring Cloud-telepítés létrehozása

Új központi telepítés létrehozásához használja a [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) parancsmagot. A következő példa egy nevű Azure Spring Cloud-üzembe helyezést hoz létre `default` az `gateway` alkalmazáshoz.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Azure Spring Cloud-szolgáltatás beszerzése

Az Azure Spring Cloud Service és a hozzá tartozó tulajdonságok beszerzéséhez használja a [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) parancsmagot. A következő példa adatokat kér le a megadott Azure Spring Cloud-szolgáltatásról.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Azure Spring Cloud-alkalmazás beszerzése

Az Azure Spring Cloud-alkalmazás és a hozzá tartozó tulajdonságok beszerzéséhez használja a [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) parancsmagot. Az alábbi példa a `gateway` Spring Cloud alkalmazással kapcsolatos információkat kérdezi le.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud-telepítés beszerzése

Az Azure Spring Cloud üzembe helyezésének és tulajdonságainak beszerzéséhez használja a [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) parancsmagot. Az alábbi példa a Spring Cloud üzemelő példány adatait kérdezi le `default` .

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrások nem szükségesek, az alábbi példák futtatásával törölheti őket.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Azure Spring Cloud üzemelő példány törlése

Az Azure Spring Cloud üzemelő példányának eltávolításához használja a [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) parancsmagot. A következő példa törli a nevű Azure Spring Cloud app `default` -telepítést a megadott szolgáltatáshoz és alkalmazáshoz.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Azure Spring Cloud-alkalmazás törlése

A Spring Cloud-alkalmazások eltávolításához használja a [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) parancsmagot. A következő példa törli az `gateway` alkalmazást a megadott szolgáltatásban és erőforráscsoporthoz.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Azure Spring Cloud-szolgáltatás törlése

Az Azure Spring Cloud Service eltávolításához használja a [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) parancsmagot. A következő példa törli a megadott Azure Spring Cloud-szolgáltatást.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Következő lépések

[Azure Spring Cloud fejlesztői erőforrások](spring-cloud-resources.md).
