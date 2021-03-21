---
title: Az Azure független felhőkben tárolt adatkezelés a PowerShell használatával
titleSuffix: Azure Storage
description: A Storage kezelése a kínai felhőben, a kormányzati felhőben és a német felhőben Azure PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783572"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Tárolók kezelése az Azure független felhőkben a PowerShell használatával

A legtöbb felhasználó az Azure nyilvános Felhőjét használja a globális Azure-beli üzembe helyezéshez. A Microsoft Azure a szuverenitás okai és így továbbra is független központi telepítések. Ezeket a független központi telepítéseket "környezeteknek" nevezzük. Az alábbi lista a jelenleg elérhető független felhőket ismerteti.

* [Azure Government felhő](https://azure.microsoft.com/features/gov/)
* [A 21Vianet által Kínában üzemeltetett Azure China 21Vianet-felhő](http://www.windowsazure.cn/)
* [Azure German Cloud](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Független felhő használata

Ha az Azure Storage-t az egyik független felhőben szeretné használni, az Azure Public helyett ehhez a felhőhöz kell csatlakoznia. Az Azure Public helyett a független felhők egyikének használata:

* Megadhatja azt a *környezetet* , amelyhez csatlakozni szeretne.
* Az elérhető régiók meghatározása és használata.
* A megfelelő végpont-utótagot használja, amely eltér az Azure Public-től.

A példákban Azure PowerShell modul az 0,7-es vagy újabb verziójára van szükség. Egy PowerShell-ablakban futtassa a parancsot a `Get-Module -ListAvailable Az` verzió megkereséséhez. Ha semmi sem szerepel a felsorolásban, vagy frissítenie kell, tekintse meg a [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps)című témakört.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Futtassa a [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) parancsmagot az elérhető Azure-környezetek megtekintéséhez:

```powershell
Get-AzEnvironment
```

Jelentkezzen be a fiókjába, amely hozzáfér a felhőhöz, amelyhez csatlakozni szeretne, és állítsa be a környezetet. Ez a példa bemutatja, hogyan jelentkezhet be egy olyan fiókba, amely a Azure Government felhőt használja.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

A kínai felhőhöz való hozzáféréshez használja a környezeti **AzureChinaCloud**. A német felhőhöz való hozzáféréshez használja a **AzureGermanCloud**.

Ezen a ponton, ha szüksége van egy Storage-fiók vagy más erőforrás létrehozásához szükséges helyekre, a [Get-AzLocation](/powershell/module/az.resources/get-azlocation)használatával lekérdezheti a kiválasztott felhőhöz elérhető helyeit.

```powershell
Get-AzLocation | select Location, DisplayName
```

A következő táblázat a német felhőhöz visszaadott helyszíneket mutatja be.

|Hely | Megjelenítendő név |
|----|----|
| `germanycentral` | Közép-Németország|
| `germanynortheast` | Északkelet-Németország |


## <a name="endpoint-suffix"></a>Végpont utótagja

Az egyes környezetek végpont-utótagja eltér az Azure nyilvános végponttól. Például az Azure nyilvános blob-végpont utótagja a **blob.Core.Windows.net**. A kormányzati felhő esetében a blob Endpoint utótagja **blob.Core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Végpont beolvasása Get-AzEnvironment használatával

A végpont utótagjának lekérése a [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment)használatával. A végpont a környezet *StorageEndpointSuffix* tulajdonsága.

A következő kódrészletek bemutatják, hogyan kérhető le a végpont utótagja. Az összes parancs a következőhöz hasonló módon tér vissza: "core.cloudapp.net" vagy "core.cloudapi.de", stb. A szolgáltatás eléréséhez fűzze hozzá az utótagot a Storage szolgáltatáshoz. A "queue.core.cloudapi.de" például a németországi felhőben fogja elérni a várólista-szolgáltatást.

Ez a kódrészlet az összes környezetet és a végpont utótagját kéri le.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Ez a parancs a következő eredményeket adja vissza.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

A megadott környezet összes tulajdonságának lekéréséhez hívja meg a **Get-AzEnvironment** , és adja meg a felhő nevét. Ez a kódrészlet a tulajdonságok listáját adja vissza; Keresse meg a **StorageEndpointSuffix** a listában. A következő példa a német felhőre mutat.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Az eredmények a következő értékekhez hasonlóak:

|Tulajdonság neve|Érték|
|----|----|
| Név | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Ha csak a Storage Endpoint utótag tulajdonságot szeretné lekérni, kérje le az adott felhőt, és kérjen rá egy tulajdonságot.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Ez a parancs a következő információkat adja vissza:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Végpont beolvasása egy Storage-fiókból

Megvizsgálhatja a tárolási fiók tulajdonságait is a végpontok beolvasásához:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

A kormányzati felhőben található Storage-fiók esetén a parancs a következő kimenetet adja vissza:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>A környezet beállítása után

Mostantól a PowerShell használatával kezelheti a Storage-fiókjait, és elérheti a blob, a várólista, a fájl és a tábla adatait. További információ: [az. Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot és egy Storage-fiókot ehhez a gyakorlathoz, akkor az erőforráscsoport törlésével eltávolíthatja mindkét eszközét. Az erőforráscsoport törlésével a csoportban található összes erőforrás törlődik.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

* [Felhasználói bejelentkezések megőrzése a PowerShell-munkamenetek között](/powershell/azure/context-persistence)
* [Azure Government Storage](../../azure-government/compare-azure-government-global-azure.md)
* [Microsoft Azure Government fejlesztői útmutató](../../azure-government/documentation-government-developer-guide.md)
* [Fejlesztői megjegyzések az Azure China 21Vianet alkalmazásaihoz](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Germany – dokumentáció](../../germany/germany-welcome.md)