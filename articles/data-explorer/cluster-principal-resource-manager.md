---
title: Fürtegyszerű staki fürtök hozzáadása az Azure Data Explorerhez egy Azure Resource Manager-sablon használatával
description: Ebben a cikkben megtudhatja, hogyan adhat hozzá fürtrendszeri tagokat az Azure Data Explorer hez egy Azure Resource Manager-sablon használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 22423568ab0b3b55d8d9566df4829eb6070b5f8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965046"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-an-azure-resource-manager-template"></a>Fürtegyszerű staki fürtök hozzáadása az Azure Data Explorerhez egy Azure Resource Manager-sablon használatával

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sablon](cluster-principal-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben az Azure Data Explorer fürtrendszeri tagok at egy Azure Resource Manager-sablon használatával adja hozzá.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt létrehozása](create-cluster-database-portal.md).

## <a name="azure-resource-manager-template-for-adding-a-cluster-principal"></a>Azure Resource Manager-sablon egyszerű fürthozzáadása

A következő példa egy Azure Resource Manager-sablont mutat be egy fürtegyszerű hozzáadása.  A [sablon t szerkesztheti és üzembe helyezheti az Azure Portalon](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) az űrlap használatával.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterPrincipalAssignmentName": {
            "type": "string",
            "defaultValue": "principalAssignment1",
            "metadata": {
                "description": "Specifies the name of the principal assignment"
            }
        },
        "clusterName": {
            "type": "string",
            "defaultValue": "mykustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "principalIdForCluster": {
            "type": "string",
            "metadata": {
                "description": "Specifies the principal id. It can be user email, application (client) ID, security group name"
            }
        },
        "roleForClusterPrincipal": {
            "type": "string",
            "defaultValue": "AllDatabasesViewer",
            "metadata": {
                "description": "Specifies the cluster principal role. It can be 'AllDatabasesAdmin', 'AllDatabasesViewer'"
            }
        },
        "tenantIdForClusterPrincipal": {
            "type": "string",
            "metadata": {
                "description": "Specifies the tenantId of the principal"
            }
        },
        "principalTypeForCluster": {
            "type": "string",
            "defaultValue": "User",
            "metadata": {
                "description": "Specifies the principal type. It can be 'User', 'App', 'Group'"
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/principalAssignments",
            "apiVersion": "2019-11-09",
            "name": "[concat(parameters('clusterName'), '/', parameters('clusterPrincipalAssignmentName'))]",
            "properties": {
                "principalId": "[parameters('principalIdForCluster')]",
                "role": "[parameters('roleForClusterPrincipal')]",
                "tenantId": "[parameters('tenantIdForClusterPrincipal')]",
                "principalType": "[parameters('principalTypeForCluster')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

* [Adatbázis-biztonsági tagok hozzáadása](database-principal-resource-manager.md)
