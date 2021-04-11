---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967122"
---
1. `az group create`Erőforráscsoport létrehozásához használja a parancsot:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Ha szeretné, a "eastus" lehetőségre kattintva megváltoztathatja a kívánt helyet.

1. `az keyvault create`A Key Vault létrehozásához használja a következőt:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Cserélje le az értékét az `<your-unique-keyvault-name>` összes Azure-beli egyedi névre. A személyes vagy a cég nevét általában más számokkal és azonosítókkal együtt használja. 

1. Hozzon létre egy környezeti változót, amely megadja a Key Vault nevét a kódnak:

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```