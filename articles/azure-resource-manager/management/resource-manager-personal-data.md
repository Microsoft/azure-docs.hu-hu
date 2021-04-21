---
title: Személyes adatok
description: Ismerje meg, hogyan kezelheti a Azure Resource Manager személyes adatait.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 9087d3e46f38aab3de7774ea341ebd9cbc2d7d1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785954"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>A felhasználókhoz társított személyes Azure Resource Manager

A bizalmas információk felfedése érdekében törölje az üzemelő példányok, erőforráscsoportok vagy címkék által megadott személyes adatokat. Azure Resource Manager olyan műveleteket biztosít, amelyek lehetővé teszi az üzemelő példányok, erőforráscsoportok vagy címkék által biztosított személyes adatok kezelését.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Személyes adatok törlése az üzembe helyezési előzményekben

Az üzemelő példányok Resource Manager megőrzi a paraméterértékeket és az állapotüzeneteket az üzembe helyezési előzményekben. Ezek az értékek mindaddig megmaradnak, amíg nem törli az üzembe helyezést az előzményekből. Ha meg kell tudni, hogy megadott-e személyes adatokat ezekben az értékekben, sorolja fel az üzemelő példányokat. Ha személyes adatokat talál, törölje az üzemelő példányokat az előzményekből.

Az **előzményekben található üzemelő példányok** listához használja a következőt:

* [List By Resource Group](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

Az **üzemelő példányok előzményekből** való törléséhez használja a következőt:

* [Törlés](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Az erőforráscsoportok nevében szereplő személyes adatok törlése

Az erőforráscsoport neve mindaddig megmarad, amíg nem törli az erőforráscsoportot. Ha meg kell tudni, hogy megadott-e személyes adatokat a nevekben, listából láthatja az erőforráscsoportokat. Ha személyes adatokat [talál,](move-resource-group-and-subscription.md) helyezze át az erőforrásokat egy új erőforráscsoportba, és törölje azt az erőforráscsoportot, amely nevében személyes adatok vannak.

Az **erőforráscsoportok listához használja** a következőt:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az_group_list)

Az **erőforráscsoportok törléséhez használja** a következőt:

* [Törlés](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az_group_delete)

## <a name="delete-personal-data-in-tags"></a>Személyes adatok törlése címkékben

A címkék nevei és értékei mindaddig megmaradnak, amíg nem törli vagy módosítja a címkét. A címkék listájával láthatja, hogy megadott-e személyes adatokat a címkékben. Ha személyes adatokat talál, törölje a címkéket.

A címkék **listához használja** a következőt:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az_tag_list)

A címkék **törléséhez használja** a következőt:

* [Törlés](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az_tag_delete)

## <a name="next-steps"></a>Következő lépések
* Az áttekintést a Azure Resource Manager a Mi [a Resource Manager?](overview.md)