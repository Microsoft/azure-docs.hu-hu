---
title: Személyes adatok
description: Megtudhatja, hogyan kezelheti Azure Resource Manager műveletekhez társított személyes adatok kezelését.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 1e531f7cd9992536bcc191637111761c5bbdefa2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693701"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Azure Resource Managerhoz társított személyes adatai kezelése

A bizalmas adatok kiírásának elkerüléséhez törölje a központi telepítések, erőforráscsoportok vagy címkék által megadott személyes adatokat. Azure Resource Manager olyan műveleteket biztosít, amelyek lehetővé teszik a központi telepítések, erőforráscsoportok vagy címkék által megadott személyes adatok kezelését.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Személyes adatértékek törlése a telepítési előzményekben

Központi telepítések esetén a Resource Manager a telepítési előzményekben megőrzi a paramétereket és az állapotüzenetek értékét. Ezek az értékek mindaddig megmaradnak, amíg el nem távolítja az üzembe helyezést az előzményekből. Ha szeretné megtekinteni, hogy az ilyen értékekben adta-e meg a személyes adatait, sorolja fel a központi telepítéseket. Ha személyes adatok találhatók, törölje a központi telepítéseket az előzményekből.

Az előzményekben lévő **központi telepítések** listázásához használja a következőt:

* [Csoportosítás erőforráscsoport szerint](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az üzembe helyezési csoport listája](/cli/azure/deployment/group#az_deployment_group_list)

Az előzményekből való **központi telepítés** törléséhez használja a következőt:

* [Törlés](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az üzembe helyezési csoport törlése](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Személyes adatértékek törlése az erőforráscsoport-nevekben

Az erőforráscsoport neve mindaddig megmarad, amíg nem törli az erőforráscsoportot. Ha szeretné megtekinteni, hogy a nevekben adta-e meg a személyes adatforrásokat, sorolja fel az erőforráscsoportot. Ha személyes adatforrásokat talál, [Helyezze át az erőforrásokat](move-resource-group-and-subscription.md) egy új erőforráscsoporthoz, és törölje az erőforráscsoportot a személyes adattal a névben.

Az **erőforráscsoportok** listázásához használja a következőt:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az Group List](/cli/azure/group#az-group-list)

Az **erőforráscsoportok** törléséhez használja a következőt:

* [Törlés](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Személyes adatértékek törlése a címkékben

A címkék nevei és értékei mindaddig megmaradnak, amíg nem törli vagy nem módosítja a címkét. Ha szeretné megtekinteni, hogy a címkéket adta-e meg személyes adatként, sorolja fel a címkéket. Ha személyes adatként keres, törölje a címkéket.

**Címkék** listázásához használja a következőt:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az az tag List](/cli/azure/tag#az-tag-list)

**Címkék** törléséhez használja a következőt:

* [Törlés](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Következő lépések
* A Azure Resource Manager áttekintése: [Mi az a Resource Manager?](overview.md)