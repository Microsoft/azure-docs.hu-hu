---
title: Felügyelt alkalmazás definíciójának létrehozása – Azure CLI
description: Egy Azure CLI-példaszkprogramot biztosít, amely létrehoz egy felügyelt alkalmazásdefiníciót az előfizetésben.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2430b14ce3a3ba578787cefa85d95475c3e9b920
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775456"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Felügyelt alkalmazás definíciójának létrehozása az Azure CLI használatával

Ez a szkript közzéteszi egy felügyelt alkalmazás definícióját egy szolgáltatáskatalógusban. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja a felügyelt alkalmazás definíciójának létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az managedapp definition create](/cli/azure/managedapp/definition#az_managedapp_definition_create) | Létrehozza egy felügyelt alkalmazás definícióját. Megadja a szükséges fájlokat tartalmazó csomagot. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
