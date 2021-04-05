---
title: Rövid útmutató – Azure Analysis Services létrehozása a PowerShell-Azure Analysis Services használatával | Microsoft Docs
description: Ez a rövid útmutató azt ismerteti, hogyan hozható létre Azure Analysis Services-kiszolgáló a PowerShell használatával
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 737649538aaf82352e27aec6220b13ba355a7a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89229327"
---
# <a name="quickstart-create-a-server---powershell"></a>Gyors útmutató: Kiszolgáló létrehozása – PowerShell

Ez a rövid útmutató bemutatja, hogyan használhatja a PowerShellt a parancssorból Azure Analysis Services-kiszolgáló létrehozásához az Azure-előfizetésben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-előfizetés**: a fiók létrehozásához látogasson el az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/offers/ms-azr-0044p/) .
- **Azure Active Directory**: Előfizetésének egy Azure Active Directory-bérlőhöz kell tartoznia, és abban a könyvtárban kell fiókkal rendelkeznie. További információ: [Hitelesítés és felhasználói engedélyek](analysis-services-manage-users.md).
- **Azure PowerShell**. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. A telepítéshez vagy frissítéshez olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

## <a name="import-azanalysisservices-module"></a>Importálás az. AnalysisServices modul

Ha kiszolgálót szeretne létrehozni az előfizetésben, használja az az [. AnalysisServices](/powershell/module/az.analysisservices) modult. Töltse be az az. AnalysisServices modult a PowerShell-munkamenetbe.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancs használatával. Kövesse a képernyőn megjelenő utasításokat.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) egy olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A kiszolgáló létrehozásakor meg kell adnia egy erőforráscsoportot az előfizetésén belül. Ha még nem rendelkezik erőforráscsoporthoz, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs használatával. A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot az USA nyugati régiójában.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>A kiszolgáló létrehozása

Hozzon létre egy új kiszolgálót a [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. A következő példában létrehozunk egy myServer nevű kiszolgálót a myResourceGroup erőforráscsoportban az USA nyugati régiójában, D1 (ingyenes) szinten. A kiszolgáló rendszergazdája philipc@adventureworks.com.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiszolgálót eltávolíthatja az előfizetésből a [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) parancs használatával. Ha a gyűjteménybe tartozó további rövid útmutatókkal és oktatóanyagokkal szeretné folytatni, ne távolítsa el a kiszolgálót. A következő példa eltávolítja az előző lépésben létrehozott kiszolgálót.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudta, hogyan hozhat létre kiszolgálót Azure-előfizetésében a PowerShell használatával. Most, hogy már rendelkezik kiszolgálóval, biztonságosabbá teheti azt egy (nem kötelező) kiszolgálótűzfal konfigurálásával. Hozzáadhat a kiszolgálóhoz egy alapszintű minta adatmodellt is közvetlenül a portálról. A mintamodell azért hasznos, mert segít megismerni a modell adatbázis-szerepkörök konfigurálását és tesztelni az ügyfélkapcsolatokat. További tudnivalókért folytassa a mintamodell hozzáadását ismertető oktatóanyaggal.

> [!div class="nextstepaction"]
> [Rövid útmutató: Kiszolgáló tűzfalának konfigurálása – Portal](analysis-services-qs-firewall.md)      