---
title: 'Gyors útmutató: Azure API üzembe helyezése a FHIR a PowerShell használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR a PowerShell használatával.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: ca20346f4c2968eac5e7308cb05b59f968a1c63e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018566"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>Gyors útmutató: Azure API üzembe helyezése a FHIR a PowerShell használatával

Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe az Azure API-t a FHIR a PowerShell használatával.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Az Azure API regisztrálása a FHIR erőforrás-szolgáltatóhoz

Ha az `Microsoft.HealthcareApis` erőforrás-szolgáltató még nincs regisztrálva az előfizetéséhez, regisztrálhatja a következővel:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Az Azure API for FHIR üzembe helyezése

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> A telepített PowerShell-modul verziójától függően `Az` Előfordulhat, hogy a kiépített FHIR-kiszolgáló a [helyi RBAC](configure-local-rbac.md) használatára van konfigurálva, és az aktuálisan bejelentkezett PowerShell-felhasználó be van ÁLLÍTVA a telepített FHIR szolgáltatás engedélyezett azonosító objektum-azonosítóinak listájában. Azt javasoljuk, hogy az [Azure RBAC](configure-azure-rbac.md) -t használja az adatsík szerepkörök hozzárendeléséhez, és előfordulhat, hogy a telepítés után törölnie kell ezt a felhasználói objektumazonosítót az Azure RBAC mód engedélyezéséhez.


## <a name="fetch-capability-statement"></a>Beolvasási képesség nyilatkozata

A FHIR-képesség nyilatkozatának beolvasásával ellenőrizheti, hogy a FHIR-fiók Azure API-ja fut-e:

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az erőforráscsoportot az alábbi lépésekkel:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezte a FHIR készült Azure API-t az előfizetésében. Ha további beállításokat szeretne megadni a FHIR készült Azure API-ban, folytassa a további beállítások útmutatóval. Ha készen áll a FHIR készült Azure API használatára, olvassa el az alkalmazások regisztrálásával foglalkozó témakört.

>[!div class="nextstepaction"]
>[További beállítások a FHIR készült Azure API-ban](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Alkalmazások regisztrálása – áttekintés](fhir-app-registration.md)
