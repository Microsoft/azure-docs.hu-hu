---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482129"
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

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
