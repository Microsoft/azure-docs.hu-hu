---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179343"
---
## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

```powershell
Connect-AzAccount
```

Ha nem tudja, melyik helyet szeretné használni, kilistázhatja az elérhető helyeket. A következő példakóddal jelenítse meg a helyek listáját, és keresse meg azt, amelyiket használni szeretné. Ez a példa az **eastus** helyet használja. Tárolja a helyet egy változóban, és használja azt, így szükség esetén csak egy helyen kell módosítania az adatokat.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy standard, általános célú Storage-fiókot LRS-replikációval a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)használatával. Következő lépésként kérje le a tárfiókkörnyezetet, amely meghatározza a használni kívánt tárfiókot. Ha a tárfiókokkal való munka során erre a környezetre hivatkozik, nem kell minden alkalommal megadnia a hitelesítő adatokat. A következő példa használatával egy *mystorageaccount* nevű tárfiókot hozhat létre helyileg redundáns tárolással (LRS) és (alapértelmezés szerint bekapcsolt) blobtitkosítással.

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
