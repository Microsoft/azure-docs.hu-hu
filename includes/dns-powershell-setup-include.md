---
title: Azure DNS – PowerShell-fájl belefoglalása
description: Azure DNS – PowerShell-fájl belefoglalása
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 32c516ccee3a9f4f7604a3e330285703a776b47d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67133370"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Az Azure PowerShell beállítása az Azure DNS-hez

### <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Az Azure Resource Manager PowerShell-parancsmagjainak legújabb verzióit kell telepítenie. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azureps-cmdlets-docs) foglalkozó témakörben talál.

Emellett a privát zónák (nyilvános előzetes verzió) használatához meg kell győződnie róla, hogy rendelkezik az alábbi PowerShell-modulokkal és verziókkal. 
* AzureRM.Dns – [4.1.0-s](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0) vagy újabb verzió
* AzureRM.Network – [5.4.0-s](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0) vagy újabb verzió

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
A fenti parancsok kimenetének meg kell mutatnia, hogy a AzureRM. DNS verziója 4.1.0 vagy újabb verziójú, a AzureRM. Network pedig 5.4.0 vagy újabb verziójú.  

Ha a rendszeren korábbi verziók vannak telepítve, telepítheti az Azure PowerShell legújabb verzióját, vagy letöltheti és telepítheti a fenti modulokat a PowerShell-galériából a fenti hivatkozásokkal, amelyek a modulverziók mellett találhatók. Ezután az alábbi parancsokkal telepítheti őket. Mindkét modul szükséges, és teljes mértékben kompatibilis a korábbi verziókkal. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Jelentkezzen be az Azure-fiókjába

Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. További információ: [Bejelentkezés a AzureRM](/powershell/azure/azurerm/authenticate-azureps).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Válassza ki az előfizetést
 
Keresse meg a fiókot az előfizetésekben.

```powershell
Get-AzureRmSubscription
```

Válassza ki, hogy melyek Azure-előfizetését használja.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport adjon meg egy helyet. Ez a hely lesz az erőforráscsoport erőforrásainak alapértelmezett helye. Mivel azonban minden DNS-erőforrás globális, nem pedig regionális, az erőforráscsoport kiválasztott helye nincs hatással az Azure DNS szolgáltatásra.

Ezt a lépést kihagyhatja, ha egy meglévő erőforráscsoportot használ.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása

Az Azure DNS szolgáltatást a Microsoft.Network erőforrás-szolgáltató kezeli. Az Azure DNS használatbavétele előtt az Azure-előfizetést regisztrálni kell ennek az erőforrás-szolgáltatónak a használatához. Ez a műveletet minden egyes előfizetés esetén csak egyszer kell elvégezni.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
